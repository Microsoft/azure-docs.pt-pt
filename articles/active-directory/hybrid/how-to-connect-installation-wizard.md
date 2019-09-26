---
title: Executando novamente o assistente de instalação do Azure AD Connect | Microsoft Docs
description: Explica como o assistente de instalação funciona na segunda vez em que você o executa.
keywords: O assistente de instalação do Azure AD Connect permite que você defina as configurações de manutenção na segunda vez em que você a executa
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
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290922"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização de Azure AD Connect: Executar o assistente de instalação pela segunda vez
Na primeira vez que você executar o assistente de instalação do Azure AD Connect, ele o guiará sobre como configurar sua instalação. Se você executar o assistente de instalação novamente, ele oferecerá opções de manutenção.

>[!IMPORTANT]
>Lembre-se de que não é possível executar o assistente de instalação enquanto uma sincronização está em andamento.  Verifique se uma sincronização não está em execução antes de iniciar o assistente.

Você pode encontrar o assistente de instalação no menu Iniciar chamado **Azure ad Connect**.

![Menu Iniciar](./media/how-to-connect-installation-wizard/startmenu.png)

Ao iniciar o assistente de instalação, você verá uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/how-to-connect-installation-wizard/additionaltasks.png)

Se você tiver instalado o ADFS com Azure AD Connect, ainda terá mais opções. As opções adicionais que você tem para o ADFS são documentadas no [Gerenciamento do ADFS](how-to-connect-fed-management.md#manage-ad-fs).

Selecione uma das tarefas e clique em **Avançar** para continuar.

> [!IMPORTANT]
> Enquanto o assistente de instalação estiver aberto, todas as operações no mecanismo de sincronização serão suspensas. Lembre-se de fechar o assistente de instalação assim que tiver concluído as alterações de configuração.
>
>

## <a name="view-current-configuration"></a>Ver configuração atual
Essa opção fornece uma exibição rápida das opções atualmente configuradas.

![Página com uma lista de todas as opções e seu estado](./media/how-to-connect-installation-wizard/viewconfig.png)

Clique em **anterior** para voltar. Se você selecionar **sair**, fechará o assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalizar as opções de sincronização
Essa opção é usada para fazer alterações na configuração de sincronização. Você verá um subconjunto de opções do caminho de instalação de configuração personalizada. Você verá essa opção mesmo que tenha usado a instalação expressa inicialmente.

* [Adicionar mais diretórios](how-to-connect-install-custom.md#connect-your-directories). Para remover um diretório, consulte [excluir um conector](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Altere a filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Remova a filtragem de grupo.
* [Alterar recursos opcionais](how-to-connect-install-custom.md#optional-features).

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Essas opções são:

* Altere o atributo a ser usado para userPrincipalName e sourceAnchor.
* Altere o método de junção para objetos de uma floresta diferente.
* Habilite a filtragem baseada em grupo.

## <a name="refresh-directory-schema"></a>Atualizar esquema do diretório
Essa opção será usada se você tiver alterado o esquema em uma de suas florestas de AD DS locais. Por exemplo, você pode ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos de dispositivo. Nesse caso, você precisa instruir Azure AD Connect a ler o esquema novamente de AD DS e atualizar seu cache. Essa ação também gera novamente as regras de sincronização. Se você adicionar o esquema do Exchange, por exemplo, as regras de sincronização para o Exchange serão adicionadas à configuração.

Quando você seleciona essa opção, todos os diretórios na sua configuração são listados. Você pode manter a configuração padrão e atualizar todas as florestas ou desmarcar algumas delas.

![Página com uma lista de todos os diretórios no ambiente](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar o modo de teste
Essa opção permite habilitar e desabilitar o modo de preparo no servidor. Mais informações sobre o modo de preparo e como ele é usado podem ser encontradas em [operações](how-to-connect-sync-staging-server.md).

A opção mostra se o preparo está habilitado ou desabilitado no momento:  
![Opção que também está mostrando o estado atual do modo de preparo](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione essa opção e marque ou desmarque a caixa de seleção.  
![Opção que também está mostrando o estado atual do modo de preparo](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar entrada do usuário
Essa opção permite que você altere o método de entrada do usuário de e para a sincronização de hash de senha, autenticação de passagem ou Federação. Não é possível alterar para **não configurar**.

Para obter mais informações sobre essa opção, consulte [entrada do usuário](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração usado pela sincronização de Azure AD Connect em [noções básicas sobre provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: Entender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
