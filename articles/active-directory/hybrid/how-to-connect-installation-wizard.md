---
title: Executar novamente o Azure AD Connect Assistente de instalação | Documentos da Microsoft
description: Explica como o Assistente de instalação funciona a segunda vez que o executa.
keywords: O Assistente de instalação do Azure AD Connect permite configurar definições de manutenção na segunda vez que executá-lo
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff2caae7cb387f4f0d88cf059d01ad28861b9ad
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191992"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização do Azure AD Connect: Executar o assistente de instalação pela segunda vez
Na primeira vez que executar o Assistente de instalação do Azure AD Connect, ele explica como configurar a sua instalação. Se executar novamente o Assistente de instalação, ele oferece opções para manutenção.

Encontrará o Assistente de instalação no menu Iniciar com o nome **do Azure AD Connect**.

![Menu Iniciar](./media/how-to-connect-installation-wizard/startmenu.png)

Quando iniciar o Assistente de instalação, verá uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/how-to-connect-installation-wizard/additionaltasks.png)

Se tiver instalado o AD FS com o Azure AD Connect, terá opções ainda mais. As opções adicionais que tem para ADFS é documentado em [gestão do AD FS](how-to-connect-fed-management.md#manage-ad-fs).

Selecione uma das tarefas e clique em **seguinte** para continuar.

> [!IMPORTANT]
> Embora tenha de abrir o Assistente de instalação, todas as operações no motor de sincronização são suspensos. Certifique-se de que fechar o Assistente de instalação, assim que concluir as alterações de configuração.
>
>

## <a name="view-current-configuration"></a>Ver configuração atual
Esta opção dá-lhe uma vista rápida da sua opções que estão configuradas.

![Página com uma lista de todas as opções e o respetivo estado](./media/how-to-connect-installation-wizard/viewconfig.png)

Clique em **Previous** para voltar atrás. Se selecionou **saída**, fechar o Assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalizar as opções de sincronização
Esta opção é utilizada para efetuar alterações à configuração de sincronização. Verá um subconjunto de opções do caminho de instalação de configuração personalizada. Verá esta opção, mesmo se tiver utilizado a instalação rápida inicialmente.

* [Adicionar mais diretórios](how-to-connect-install-custom.md#connect-your-directories). Para remover um diretório, consulte [eliminar um conector](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Alterar o domínio e UO filtragem](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Remova a filtragem de grupos.
* [Alterar as funcionalidades opcionais](how-to-connect-install-custom.md#optional-features).

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Estas opções são:

* Altere o atributo a utilizar para userPrincipalName e sourceAnchor.
* Altere o método de junção para objetos de floresta diferente.
* Ative a filtragem baseada em grupo.

## <a name="refresh-directory-schema"></a>Atualizar esquema do diretório
Esta opção é utilizada se tiver alterado o esquema de uma de suas instalações florestas do AD FS. Por exemplo, poderá ter instalado o Exchange ou atualizados para um esquema do Windows Server 2012 com objetos de dispositivo. Neste caso, precisa instruir o Azure AD Connect para ler o esquema novamente a partir do AD DS e atualizar seu cache. Esta ação também Regenera as regras de sincronização. Se adicionar o esquema do Exchange, por exemplo, as regras de sincronização para o Exchange são adicionadas à configuração.

Quando seleciona esta opção, são listados todos os diretórios na sua configuração. Pode manter a predefinição e atualizar todas as florestas ou anule a seleção de alguns deles.

![Página com uma lista de todos os diretórios no ambiente](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar o modo de teste
Esta opção permite-lhe ativar e desativar o modo de teste no servidor. Podem encontrar mais informações sobre o teste de modo e como são utilizadas no [operações](how-to-connect-sync-staging-server.md).

A opção mostra se o teste está atualmente ativado ou desativado:  
![Opção que também está a mostrar o estado atual do modo de teste](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione esta opção e selecione ou desmarque a caixa de verificação.  
![Opção que também está a mostrar o estado atual do modo de teste](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar início de sessão do utilizador
Esta opção permite-lhe alterar o método de início de sessão do utilizador de e para sincronização de hash de palavra-passe, autenticação pass-through ou Federação. Não é possível alterar para **não configure**.

Para obter mais informações sobre esta opção, consulte [sessão do utilizador](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o modelo de configuração utilizado pelo Azure AD Connect sync na [aprovisionamento declarativo da compreensão](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
