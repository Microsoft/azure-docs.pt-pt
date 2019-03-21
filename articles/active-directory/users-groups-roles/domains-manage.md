---
title: Adicionar e verificar nomes de domínio personalizado - Azure Active Directory | Documentos da Microsoft
description: Conceitos de gestão e guias de instruções para a gestão de um nome de domínio no Azure Active Directory
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
ms.openlocfilehash: 908ae768ae471ab6f49452c99323c31d34772d45
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201856"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerir nomes de domínio personalizados no Azure Active Directory

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ele faz parte de um endereço de e-mail ou nome de utilizador para um utilizador, a parte do endereço de um grupo e, às vezes, faz parte do URI de ID de aplicação para uma aplicação. Um recurso no Azure Active Directory (Azure AD) pode incluir um nome de domínio que é propriedade de diretório que contém o recurso. Apenas um Administrador Global pode gerir domínios no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Defina o nome de domínio primário de diretório do Azure AD

Quando é criado o seu diretório, o nome de domínio inicial, como "contoso.onmicrosoft.com", também é o nome de domínio primário. O domínio principal é o nome de domínio predefinido para um novo utilizador ao criar um novo utilizador. Definir um nome de domínio primário simplifica o processo para um administrador criar novos utilizadores no portal. Para alterar o nome de domínio primário:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um Administrador Global do diretório.
2. Selecione **Azure Active Directory**.
3. Selecione **Nomes de domínio personalizados**.
  
   ![Abrir a página de gestão de utilizadores](./media/domains-manage/add-custom-domain.png)
4. Selecione o nome do domínio que pretende que o domínio principal.
5. Selecione o **tornar o primário** comando. Certifique-se à sua escolha quando lhe for pedido.
  
   ![Tornar um domínio, nome principal](./media/domains-manage/make-primary-domain.png)

Pode alterar o nome de domínio primário para o diretório para qualquer domínio personalizado verificado que não está federado. Alterar o domínio principal para o seu diretório não será alterado o nome de utilizador para todos os utilizadores existentes.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Adicionar nomes de domínio personalizado ao seu inquilino do Azure AD

Pode adicionar até 900 nomes de domínio gerido. Se estiver a configurar todos os seus domínios para federação com o Active Directory no local, pode adicionar até 450 nomes de domínio em cada diretório.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado

Se pretender adicionar um nome de domínio de terceiro nível, como 'europe.contoso.com' para o seu diretório, deve primeiro adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio automaticamente é verificado pelo Azure AD. Para ver que o subdomínio adicionou é verificado, atualize a lista de domínio no browser.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se alterar a entidade de registo DNS para o seu nome de domínio personalizado

Se alterar as entidades de registo DNS, existem não existem tarefas de configuração adicionais no Azure AD. Pode continuar a utilizar o nome de domínio com o Azure AD sem interrupção. Se utilizar o seu nome de domínio personalizado com o Office 365, o Intune ou outros serviços que dependem de nomes de domínio personalizados no Azure AD, consulte a documentação para os serviços.

## <a name="delete-a-custom-domain-name"></a>Eliminar um nome de domínio personalizado

Pode eliminar um nome de domínio personalizado do Azure AD, se sua organização já não utiliza esse nome de domínio ou se precisar de utilizar esse nome de domínio com o outro Azure AD.

Para eliminar um nome de domínio personalizado, primeiro tem de se certificar de que não existem recursos no seu diretório contam com o nome de domínio. Não é possível eliminar um nome de domínio do diretório se:

* Qualquer utilizador tem um nome de utilizador, o endereço de e-mail ou o endereço de proxy que inclui o nome de domínio.
* Qualquer grupo tem um endereço de e-mail ou o endereço de proxy que inclui o nome de domínio.
* Qualquer aplicação no seu Azure AD tem um URI de ID, que inclui o nome de domínio do aplicativo.

Tem de alterar ou eliminar todos os tais recursos no diretório do Azure AD antes de poder eliminar o nome de domínio personalizado.

### <a name="forcedelete-option"></a>Opção de ForceDelete

Pode **ForceDelete** um nome de domínio a [Centro de administração do Azure AD](https://aad.portal.azure.com) ou utilizar [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Estas opções de utilizar uma operação assíncrona e atualizar todas as referências de nome de domínio personalizado, como "user@contoso.com"para o nome de domínio predefinido inicial, tais como"user@contoso.onmicrosoft.com." 

Para chamar **ForceDelete** no portal do Azure, tem de garantir que há menos de 1000 referências para o nome de domínio, e todas as referências em que o Exchange é o serviço de aprovisionamento tem de ser atualizadas ou removidas no [ Centro de administração do Exchange](https://outlook.office365.com/ecp/). Isto inclui os grupos de segurança de Exchange Mail-Enabled e listas distribuídas; Para obter mais informações, consulte [remover grupos de segurança com capacidade de correio](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Além disso, o **ForceDelete** operação não ter êxito se uma das seguintes opções for verdadeira:

* Comprou um domínio através de serviços de assinatura de domínio do Office 365
* É um parceiro administrar em nome de outro inquilino do cliente

As seguintes ações são efetuadas como parte da **ForceDelete** operação:

* Muda o nome o UPN, endereço de correio eletrónico e ProxyAddress de utilizadores com referências para o nome de domínio personalizado para o nome de domínio predefinido inicial.
* Muda o nome do endereço de correio eletrónico de grupos com referências para o nome de domínio personalizado para o nome de domínio predefinido inicial.
* Muda o nome identifierUris dos aplicativos com referências para o nome de domínio personalizado para o nome de domínio predefinido inicial.

É devolvido um erro quando:

* O número de objetos a possível mudar o nome é superior a 1000
* Um dos aplicativos para possível mudar o nome é uma aplicação multi-inquilino

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que a eliminação de domínio falha com um erro que indica que tenho de grupos do Exchange controlada neste nome de domínio?** <br>
**R:** Hoje em dia, determinados grupos como grupos de segurança com capacidade de correio e distribuídas listas são aprovisionados pelo Exchange e precisem ser limpos manualmente no [Centro de administração do Exchange (EAC)](https://outlook.office365.com/ecp/). Aqui pode ser remanescentes ProxyAddresses que contam com o nome de domínio personalizado e terá de ser atualizados manualmente para outro nome de domínio. 

**P: Eu estou conectado como administrador\@contoso.com, mas eu não é possível eliminar o nome de domínio "contoso.com"?**<br>
**R:** Não é possível referenciar o nome de domínio personalizado que está a tentar eliminar o seu nome de conta de utilizador. Certifique-se de que a conta de Administrador Global está a utilizar o nome de domínio predefinido inicial (. onmicrosoft.com) como admin@contoso.onmicrosoft.com. Inicie sessão com um Administrador Global diferente da conta que, como admin@contoso.onmicrosoft.com ou outro nome de domínio personalizado, como "fabrikam.com" onde está a conta admin@fabrikam.com.

**P: Cliquei no botão de domínio de eliminar e veja `In Progress` estado para a operação de eliminação. Quanto tempo demora? O que acontece se falhar?**<br>
**R:** A operação de eliminação de domínio é uma tarefa em segundo plano assíncronas que muda o nome de todas as referências para o nome de domínio. Ele deve ser concluído dentro de um ou dois minutos. Se falhar a eliminação do domínio, certifique-se de que não tem de:

* Aplicações configuradas no nome de domínio com o appIdentifierURI
* Qualquer grupo com capacidade de correio que referencia o nome de domínio personalizado
* Mais de 1000 referências para o nome de domínio

Se acha que ainda não foram cumpridos qualquer uma das condições, manualmente limpar as referências e tente novamente a eliminar o domínio.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utilize o PowerShell ou Graph API para gerir os nomes de domínio

Também é possível concluir a maioria das tarefas de gestão para nomes de domínio no Azure Active Directory através do PowerShell da Microsoft, ou usando programaticamente o Azure AD Graph API.

* [Utilizar o PowerShell para gerir nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Com o Graph API para gerir nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Passos Seguintes

* [Adicionar nomes de domínio personalizados](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Remover grupos de segurança com capacidade de correio do Exchange no Centro de administração do Exchange num nome de domínio personalizado no Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete um nome de domínio personalizado com o Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
