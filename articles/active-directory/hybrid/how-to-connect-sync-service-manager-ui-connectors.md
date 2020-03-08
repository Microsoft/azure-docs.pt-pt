---
title: Conectores no Azure AD Synchronization Service Manager UI [ Gestor de Serviços de Sincronização da AD Azure ] Microsoft Docs'
description: Compreenda o separador Conectores no Gestor de Serviços de Sincronização para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376373"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Utilização de conectores com o Gestor de Serviços de Sincronização de Ligação AD Azure

![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

O separador Conectores é utilizado para gerir todos os sistemas a que o motor sincronizado está ligado.

## <a name="connector-actions"></a>Ações de conector
| Ação | Comentário |
| --- | --- |
| Criar |Não utilize. Para ligar a florestas aD adicionais, utilize o assistente de instalação. |
| Propriedades |Usado para filtragem de domínio e U. |
| [Eliminar](#delete) |Utilizado para apagar os dados no espaço do conector ou para eliminar a ligação a uma floresta. |
| [Configure perfis de execução](#configure-run-profiles) |Exceto a filtragem de domínio, nada para configurar aqui. Pode utilizar esta ação para ver perfis de execução já configurados. |
| Executar |Costumava começar uma série única de perfil. |
| Parar |Para um Connector atualmente com um perfil. |
| Conector de exportação |Não utilize. |
| Conector de Importação |Não utilize. |
| Atualizar O Conector |Não utilize. |
| Refresh Schema |Refresca o esquema em cache. É preferível utilizar a opção no assistente de instalação, uma vez que também atualiza as regras de sincronização. |
| [Pesquisar Espaço conector](#search-connector-space) |Usado para encontrar objetos e seguir um objeto e seus dados através do sistema. |

### <a name="delete"></a>Eliminar
A ação de eliminação é usada para duas coisas diferentes.  
![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

A opção Eliminar o **espaço do conector apenas** remove todos os dados, mas mantém a configuração.

A opção Eliminar o Connector e o **espaço do conector** remove os dados e a configuração. Esta opção é usada quando já não quer ligar-se a uma floresta.

Ambas as opções sincronizam todos os objetos e atualizam os objetos metaversos. Esta ação é uma operação de longa duração.

### <a name="configure-run-profiles"></a>Configurar Perfis de Execução
Esta opção permite-lhe ver os perfis de execução configurados para um Connector.

![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Pesquisar Espaço conector
A ação espacial do conector de pesquisa é útil para encontrar objetos e problemas de dados.

![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Comece por selecionar um **alcance**. Pode pesquisar com base em dados (RDN, DN, Anchor, Sub-Tree) ou no estado do objeto (todas as outras opções).  
![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Se, por exemplo, fizer uma pesquisa sub-árvore, obtém todos os objetos num só OU.  
![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
A partir desta grelha pode selecionar um objeto, selecionar **propriedades,** e [segui-lo](tshoot-connect-object-not-syncing.md) a partir do espaço do conector de origem, através do metaverso, e para o espaço do conector alvo.

### <a name="changing-the-ad-ds-account-password"></a>Alterar a senha da conta AD DS
Se alterar a palavra-passe da conta, o Serviço de Sincronização deixará de poder importar/exportar alterações para a AD no local.   Pode ver o seguinte:

- O passo de importação/exportação do conector AD falha com o erro de "não iniciar credenciais".
- No Windows Event Viewer, o registo do evento da aplicação contém um erro com o Id 6000 do Evento e a mensagem "O agente de gestão "contoso.com" não foi executado porque as credenciais eram inválidas.

Para resolver o problema, atualize a conta de utilizador AD DS utilizando o seguinte:


1. Inicie o Gestor de Serviços de Sincronização (START → Serviço de Sincronização).
</br>![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Vá ao separador **Conectores.**
3. Selecione o Conector AD que está configurado para utilizar a conta AD DS.
4. Em Ações, selecione **Propriedades**.
5. No diálogo pop-up, selecione Connect to Ative Directory Forest:
6. O nome Forest indica o correspondente nas instalações ad.
7. O nome utilizador indica a conta AD DS utilizada para a sincronização.
8. Introduza a nova palavra-passe da conta AD DS na caixa de texto password ![Azure AD Connect Sync Password Utility](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Clique em OK para guardar a nova palavra-passe e reiniciar o Serviço de Sincronização para remover a antiga palavra-passe da cache de memória.



## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
