---
title: Conectores na IU do Gestor de serviço de sincronização do AD do Azure | Documentos da Microsoft
description: Compreenda o separador conectores o Synchronization Service Manager para o Azure AD Connect.
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
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431177"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Utilização de conectores com o Azure AD Connect sincronização o Service Manager

![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

O separador conectores é utilizado para gerir todos os sistemas, que o motor de sincronização está ligado.

## <a name="connector-actions"></a>Ações de conector
| Ação | Comentário |
| --- | --- |
| Criar |Não utilize. Para ligar ao florestas adicionais do AD, utilize o Assistente de instalação. |
| Propriedades |Utilizado para o domínio e a filtragem de UO. |
| [Eliminar](#delete) |Usado para optar por eliminar os dados no espaço conector ou para eliminar a ligação para uma floresta. |
| [Configurar perfis de execução](#configure-run-profiles) |Exceto para o domínio de filtragem, nada para configurar aqui. Pode utilizar esta ação para ver os perfis de execução já configurados. |
| Executar |Utilizado para iniciar uma execução pontual de um perfil. |
| Parar |Interrompe um conector atualmente em execução um perfil. |
| Conector de exportação |Não utilize. |
| Conector de importação |Não utilize. |
| Atualizar conector |Não utilize. |
| Actualizar Esquema |Atualiza o esquema em cache. É preferencial para utilizar a opção no Assistente de instalação em vez disso, desde que também as atualizações de sincronizar as regras. |
| [Procurar espaço conector](#search-connector-space) |Utilizado para localizar objetos e a seguir um objeto e os respetivos dados através do sistema. |

### <a name="delete"></a>Eliminar
A ação de eliminação é utilizada para duas coisas diferentes.  
![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

A opção **eliminar apenas o espaço conector** remove todos os dados, mas manter a configuração.

A opção **espaço de eliminar o conector e conector** remove os dados e a configuração. Esta opção é utilizada quando não pretende ligar a uma floresta mais.

Ambas as opções de sincronizar todos os objetos e atualizar os objetos de metaverso. Esta ação é uma operação de longa execução.

### <a name="configure-run-profiles"></a>Configurar perfis de execução
Esta opção permite-lhe ver os perfis de execução configurados para um conector.

![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Procurar espaço conector
A ação de espaço do conector de pesquisa é útil para localizar objetos e resolver problemas de dados.

![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Comece por selecionar uma **âmbito**. Pode pesquisar com base nos dados (RDN, DN, âncora-subárvore), ou estado do objeto (todas as outras opções).  
![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Se o fizer, por exemplo, uma pesquisa em subárvore, obtém todos os objetos numa unidade Organizacional.  
![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Dessa grade pode selecionar um objeto, selecione **propriedades**, e [segui-lo](tshoot-connect-object-not-syncing.md) partir do espaço de conector de origem, através do metaverse e para o espaço do conector de destino.

### <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe de conta do AD DS
Se alterar a palavra-passe de conta, o serviço de sincronização já não será capaz de importar/exportar alterações para o local AD.   Pode ver o seguinte:

- O passo de importação/exportação para o conector AD falha com erro de "credenciais de início de não".
- No Visualizador de eventos do Windows, o log de eventos do aplicativo contém um erro com 6000 de ID de evento e a mensagem "o agente de gestão"contoso.com"não executou uma vez que as credenciais eram inválidas."

Para resolver o problema, atualize a conta de utilizador do AD DS utilizando o seguinte:


1. Inicie o Synchronization Service Manager (serviço de sincronização do início →).
</br>![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Vá para o **conectores** separador.
3. Selecione o conector do AD que está configurado para utilizar a conta do AD DS.
4. Em ações, selecione **propriedades**.
5. Na caixa de diálogo pop-up, selecione ligar à floresta do Active Directory:
6. O nome da floresta indica o correspondente AD no local.
7. O nome de utilizador indica a conta do AD DS utilizada para sincronização.
8. Introduza a nova palavra-passe da conta do AD DS na caixa de texto palavra-passe ![do Azure AD Connect Sync encriptação chave utilitário](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Clique em OK para guardar a nova palavra-passe e reinicie o serviço de sincronização para remover a palavra-passe antiga da cache de memória.



## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
