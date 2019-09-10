---
title: Adicionar e verificar nomes de domínio personalizados-Azure Active Directory | Microsoft Docs
description: Conceitos de gerenciamento e instruções para gerenciar um nome de domínio no Azure Active Directory
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
ms.openlocfilehash: 750b49e149907f204b8b15f0b5728ab25f917743
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844510"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerenciando nomes de domínio personalizados no seu Azure Active Directory

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: faz parte de um nome de usuário ou endereço de email para um usuário, parte do endereço de um grupo e, às vezes, faz parte do URI de ID de aplicativo para um aplicativo. Um recurso no Azure Active Directory (AD do Azure) pode incluir um nome de domínio que pertence ao diretório que contém o recurso. Somente um administrador global pode gerenciar domínios no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio primário para seu diretório do Azure AD

Quando o diretório é criado, o nome de domínio inicial, como ' contoso.onmicrosoft.com ', também é o nome de domínio primário. O domínio primário é o nome de domínio padrão para um novo usuário quando você cria um novo usuário. Definir um nome de domínio primário simplifica o processo para um administrador criar novos usuários no Portal. Para alterar o nome de domínio primário:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Azure Active Directory**.
3. Selecione **Nomes de domínio personalizados**.
  
   ![Abrindo a página de gerenciamento de usuário](./media/domains-manage/add-custom-domain.png)
4. Selecione o nome do domínio que você deseja que seja o domínio primário.
5. Selecione o comando **Make Primary** . Confirme sua escolha quando solicitado.
  
   ![Tornar um nome de domínio o primário](./media/domains-manage/make-primary-domain.png)

Você pode alterar o nome de domínio primário para seu diretório para qualquer domínio personalizado verificado que não seja federado. A alteração do domínio primário para seu diretório não alterará o nome de usuário para nenhum dos usuários existentes.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Adicionar nomes de domínio personalizados ao seu locatário do Azure AD

Você pode adicionar até 900 nomes de domínio gerenciados. Se você estiver configurando todos os seus domínios para federação com Active Directory locais, poderá adicionar até 450 nomes de domínio em cada diretório.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado

Se você quiser adicionar um nome de domínio de terceiro nível, como ' europe.contoso.com ', a seu diretório, primeiro adicione e verifique o domínio de segundo nível, como contoso.com. O subdomínio é verificado automaticamente pelo Azure AD. Para ver se o subdomínio adicionado foi verificado, atualize a lista de domínios no navegador.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se você alterar o registrador de DNS para seu nome de domínio personalizado

Se você alterar os registradores de DNS, não haverá nenhuma tarefa de configuração adicional no Azure AD. Você pode continuar usando o nome de domínio com o Azure AD sem interrupção. Se você usar seu nome de domínio personalizado com o Office 365, Intune ou outros serviços que dependem de nomes de domínio personalizados no Azure AD, consulte a documentação para esses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado

Você pode excluir um nome de domínio personalizado do AD do Azure se sua organização não usa mais esse nome de domínio ou se você precisar usar esse nome de domínio com outro Azure AD.

Para excluir um nome de domínio personalizado, primeiro você deve garantir que nenhum recurso em seu diretório dependa do nome de domínio. Você não poderá excluir um nome de domínio do seu diretório se:

* Qualquer usuário tem um nome de usuário, endereço de email ou endereço de proxy que inclui o nome de domínio.
* Qualquer grupo tem um endereço de email ou endereço proxy que inclui o nome de domínio.
* Qualquer aplicativo no Azure AD tem um URI de ID de aplicativo que inclui o nome de domínio.

Você deve alterar ou excluir qualquer recurso desse tipo em seu diretório do Azure AD antes de excluir o nome de domínio personalizado.

### <a name="forcedelete-option"></a>Opção ForceDelete

Você pode **ForceDelete** um nome de domínio no [centro de administração do Azure AD](https://aad.portal.azure.com) ou usando [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Essas opções usam uma operação assíncrona e atualizam todas as referências do nome de domíniouser@contoso.compersonalizado como "" para o nome de domínio padrãouser@contoso.onmicrosoft.cominicial, como "." 

Para chamar **ForceDelete** no portal do Azure, você deve verificar se há menos de 1000 referências ao nome de domínio e quaisquer referências em que o Exchange é o serviço de provisionamento devem ser atualizadas ou removidas no [centro de administração do Exchange](https://outlook.office365.com/ecp/). Isso inclui grupos de segurança habilitados para email do Exchange e listas distribuídas; para obter mais informações, consulte [removendo grupos de segurança habilitados para email](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Além disso, a operação **ForceDelete** não terá sucesso se uma das seguintes opções for verdadeira:

* Você comprou um domínio por meio dos serviços de assinatura de domínio do Office 365
* Você é um parceiro administrando em nome de outro locatário do cliente

As ações a seguir são executadas como parte da operação **ForceDelete** :

* Renomeia o UPN, o EmailAddress e o ProxyAddress de usuários com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Renomeia o EmailAddress de grupos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Renomeia o identifierUris de aplicativos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.

Um erro é retornado quando:

* O número de objetos a serem renomeados é maior que 1000
* Um dos aplicativos a serem renomeados é um aplicativo multilocatário

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que a exclusão do domínio falha com um erro afirmando que tenho grupos mestres do Exchange nesse nome de domínio?** <br>
**R:** Hoje, determinados grupos, como grupos de segurança habilitados para email e listas distribuídas, são provisionados pelo Exchange e precisam ser limpos manualmente no [centro de administração do Exchange (Eat)](https://outlook.office365.com/ecp/). Pode haver ProxyAddresses persistentes que dependem do nome de domínio personalizado e precisarão ser atualizadas manualmente para outro nome de domínio. 

**P: Estou conectado como administrador\@contoso.com, mas não posso excluir o nome de domínio "contoso.com"?**<br>
**R:** Não é possível referenciar o nome de domínio personalizado que você está tentando excluir em seu nome de conta de usuário. Verifique se a conta de administrador global está usando o nome de domínio padrão inicial (. onmicrosoft.com) admin@contoso.onmicrosoft.com, como. Entre com uma conta de administrador global diferente que como admin@contoso.onmicrosoft.com ou outro nome de domínio personalizado, como "fabrikam.com", em que a conta é. admin@fabrikam.com

**P: Cliquei no botão excluir domínio e veja `In Progress` o status da operação de exclusão. Quanto tempo leva? O que acontecerá se falhar?**<br>
**R:** A operação excluir domínio é uma tarefa em segundo plano assíncrona que renomeia todas as referências ao nome de domínio. Ele deve ser concluído em um ou dois minutos. Se a exclusão do domínio falhar, verifique se você não tem:

* Aplicativos configurados no nome de domínio com o appIdentifierURI
* Qualquer grupo habilitado para email referenciando o nome de domínio personalizado
* Mais de 1000 referências ao nome de domínio

Se você descobrir que qualquer uma das condições não foi atendida, limpe manualmente as referências e tente excluir o domínio novamente.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usar o PowerShell ou API do Graph para gerenciar nomes de domínio

A maioria das tarefas de gerenciamento para nomes de domínio no Azure Active Directory também pode ser concluída usando o Microsoft PowerShell ou programaticamente usando o Azure AD API do Graph.

* [Usando o PowerShell para gerenciar nomes de domínio no Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Usando API do Graph para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Passos Seguintes

* [Adicionar nomes de domínio personalizados](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Remover grupos de segurança habilitados para email do Exchange no centro de administração do Exchange em um nome de domínio personalizado no Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete um nome de domínio personalizado com a API Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
