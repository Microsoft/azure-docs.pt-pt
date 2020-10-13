---
title: Conectores no Azure AD Synchronization Service Manager UI Microsoft Docs'
description: Compreenda o separador Conectores no Gestor de Serviço de Sincronização para Azure AD Connect.
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
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319882"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Utilização de conectores com o Gestor de Serviços de Sincronização AZURE AD Connect

![Screenshot que mostra o Gestor de Serviços de Sincronização.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

O separador Conectores é utilizado para gerir todos os sistemas a que o motor de sincronização está ligado.

## <a name="connector-actions"></a>Ações de conector
| Ação | Comentário |
| --- | --- |
| Criar |Não utilizar. Para ligar a florestas AD adicionais, utilize o assistente de instalação. |
| Propriedades |Utilizado para filtragem de domínio e U. |
| [Eliminar](#delete) |Utilizado para eliminar os dados no espaço do conector ou para eliminar a ligação a uma floresta. |
| [Configurar perfis de execução](#configure-run-profiles) |Exceto pela filtragem de domínios, nada para configurar aqui. Você pode usar esta ação para ver perfis de execução já configurados. |
| Executar |Costumava começar uma série única de um perfil. |
| Parar |Para um Conector atualmente a executar um perfil. |
| Conector de exportação |Não utilizar. |
| Conector de Importação |Não utilizar. |
| Conector de atualização |Não utilizar. |
| Atualizar Schema |Refresca o esquema em cache. Prefere-se utilizar a opção no assistente de instalação, uma vez que também atualiza as regras de sincronização. |
| [Procurar espaço no conector](#search-connector-space) |Usado para encontrar objetos e seguir um objeto e seus dados através do sistema. |

### <a name="delete"></a>Eliminar
A ação de eliminação é utilizada para duas coisas diferentes.  
![Screenshot que mostra a janela "Delete Connector" com "Delete connector only" selecionada.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

A opção **Eliminar o espaço do conector apenas** remove todos os dados, mas mantém a configuração.

A opção **Eliminar o conector e o espaço do conector** remove os dados e a configuração. Esta opção é usada quando já não pretende ligar-se a uma floresta.

Ambas as opções sincronizam todos os objetos e atualizam os objetos metaversos. Esta ação é uma operação de longa duração.

### <a name="configure-run-profiles"></a>Configurar perfis de execução
Esta opção permite-lhe ver os perfis de execução configurados para um Conector.

![Screenshot que mostra a janela "Configure Run Profiles" com "Delta Import" selecionada.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Procurar espaço no conector
A ação espacial do conector de pesquisa é útil para encontrar objetos e resolver problemas de dados.

![Screenshot que mostra a janela "Search Connector Space".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Comece por selecionar um **âmbito.** Pode pesquisar com base em dados (RDN, DN, Âncora, Sub-Árvore) ou no estado do objeto (todas as outras opções).  
![Screenshot que mostra o menu suspenso "Scope".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Se fizer uma pesquisa Sub-Tree, obtém todos os objetos num único U.  
![Screenshot que mostra um exemplo de uma pesquisa de "Sub-Árvore".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
A partir desta grelha pode selecionar um objeto, selecionar **propriedades**e [segui-lo](tshoot-connect-object-not-syncing.md) a partir do espaço do conector de origem, através do metaverso e até ao espaço do conector alvo.

### <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe da conta do AD DS
Se alterar a palavra-passe da conta, o Serviço de Sincronização deixará de poder importar/exportar alterações para a AD no local.   Pode ver o seguinte:

- O passo de importação/exportação para o conector AD falha com erro de "não-credenciais de arranque".
- No Windows Event Viewer, o registo do evento de aplicação contém um erro com o ID do evento 6000 e a mensagem "O agente de gestão "contoso.com" não executou porque as credenciais eram inválidas."

Para resolver o problema, atualize a conta de utilizador AD DS utilizando o seguinte:


1. Iniciar o Gestor de Serviços de Sincronização (START → Serviço de Sincronização).
</br>![Gestor de Serviço sincronizado](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Vá ao **separador Conectores.**
3. Selecione o Conector AD que está configurado para utilizar a conta DS AD.
4. Em Ações, selecione **Propriedades.**
5. No diálogo pop-up, selecione Connect to Ative Directory Forest:
6. O nome Da Floresta indica o correspondente nas instalações da AD.
7. O nome do utilizador indica a conta DS AD utilizada para sincronização.
8. Introduza a nova palavra-passe da conta DS AD na caixa de texto password ![ Azure AD Connect Sync Encryption Key Utility](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Clique em OK para guardar a nova palavra-passe e reinicie o Serviço de Sincronização para remover a senha antiga do cache de memória.



## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
