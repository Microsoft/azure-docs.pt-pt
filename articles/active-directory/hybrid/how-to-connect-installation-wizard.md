---
title: Re-executar o assistente de instalação Azure AD Connect | Microsoft Docs
description: Explica como funciona o assistente de instalação da segunda vez que o executa.
keywords: O assistente de instalação Azure AD Connect permite configurar as definições de manutenção da segunda vez que o executou
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
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306128"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect sync: Executar o assistente de instalação uma segunda vez
A primeira vez que executar o assistente de instalação Azure AD Connect, ele acompanha-o como configurar a sua instalação. Se voltar a executar o assistente de instalação, oferece opções de manutenção.

>[!IMPORTANT]
>Tenha em atenção que não pode executar o assistente de instalação enquanto estiver em curso uma sincronização.  Verifique se não está a funcionar uma sincronização antes de lançar o assistente.

Pode encontrar o assistente de instalação no menu inicial chamado **Azure AD Connect**.

![Menu Iniciar](./media/how-to-connect-installation-wizard/startmenu.png)

Quando inicia o assistente de instalação, vê uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/how-to-connect-installation-wizard/additionaltasks.png)

Se instalou a ADFS com Azure AD Connect, tem ainda mais opções. As opções adicionais que tem para a ADFS estão documentadas na [gestão da ADFS.](how-to-connect-fed-management.md#manage-ad-fs)

Selecione uma das tarefas e clique **em Seguinte** para continuar.

> [!IMPORTANT]
> Enquanto tiver o assistente de instalação aberto, todas as operações no motor de sincronização estão suspensas. Certifique-se de que fecha o assistente de instalação assim que tiver concluído as alterações de configuração.
>
>

## <a name="view-current-configuration"></a>Ver configuração atual
Esta opção dá-lhe uma visão rápida das suas opções atualmente configuradas.

![Página com uma lista de todas as opções e seu estado](./media/how-to-connect-installation-wizard/viewconfig.png)

Clique **em Antes** para voltar. Se selecionar **'Sair'**, fecha o assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalize opções de sincronização
Esta opção é utilizada para escamar a configuração da sincronização. Você vê um subconjunto de opções a partir do caminho de instalação de configuração personalizada. Você vê esta opção mesmo que tenha usado a instalação expressa inicialmente.

* [Adicione mais diretórios.](how-to-connect-install-custom.md#connect-your-directories) Para remover um diretório, consulte [Eliminar um Conector](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Alterar o domínio e a filtragem da U.](how-to-connect-install-custom.md#domain-and-ou-filtering)
* Remover a filtragem do grupo.
* [Alterar funcionalidades opcionais](how-to-connect-install-custom.md#optional-features).

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Estas opções são:

* Altere o atributo para usar para o userPrincipalName e sourceAnchor.
* Altere o método de junção de objetos de diferentes florestas.
* Ativar a filtragem baseada em grupo.

## <a name="refresh-directory-schema"></a>Refresh directy schema
Esta opção é utilizada se tiver alterado o esquema numa das suas florestas AD DS no local. Por exemplo, pode ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos do dispositivo. Neste caso, tem de instruir o Azure AD Connect a ler novamente o esquema a partir de DS AD e atualizar a sua cache. Esta ação também regenera as Regras de Sincronização. Se adicionar o esquema de Troca, como exemplo, as Regras de Sincronização de Troca são adicionadas à configuração.

Quando selecionar esta opção, todos os diretórios da sua configuração estão listados. Pode manter a definição padrão e refrescar todas as florestas ou desmarcar algumas delas.

![Página com uma lista de todos os diretórios no ambiente](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Modo de configuração
Esta opção permite-lhe ativar e desativar o modo de paragem no servidor. Mais informações sobre o modo de preparação e como é utilizado podem ser encontradas em [Operações.](how-to-connect-sync-staging-server.md)

A opção mostra se a encenação está ativada ou desativada:  
![Screenshot que mostra modo de encenação desativado.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione esta opção e selecione ou desescole a caixa de verificação.  
![Opção que também mostra o estado atual do modo de encenação](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar o pedido de in do utilizador
Esta opção permite alterar o método de entrada do utilizador de e para a sincronização de haxixe de palavra-passe, autenticação de passagem ou federação. Não pode mudar para **não configurar**.

Para obter mais informações sobre esta opção, consulte [o início de sôm.](plan-connect-user-signin.md#changing-the-user-sign-in-method)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração utilizado pela sincronização Azure AD Connect in [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
