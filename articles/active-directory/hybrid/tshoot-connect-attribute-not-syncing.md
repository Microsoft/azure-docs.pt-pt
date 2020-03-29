---
title: Troubleshoot um atributo não sincronizado no Azure AD Connect [ Ligação AD] Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com sincronização de atributos usando a tarefa de resolução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455963"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Problemas de resolução de um atributo não sincronizado no Azure AD Connect

## <a name="recommended-steps"></a>**Passos Recomendados**

Antes de investigar problemas de sincronização de atributos, vamos entender o processo de sincronização **do Azure AD Connect:**

  ![Processo de sincronização da Ligação Azure](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Connector Space, uma tabela na base de dados.
* **MV:** Metaverso, uma tabela na base de dados.
* **AD:** Diretório Ativo
* **AAD:** Diretório Ativo Azure

### <a name="synchronization-steps"></a>**Passos de sincronização**

* Importação de AD: Os objetos de Diretório ativo são introduzidos em CS AD.

* Importação de AAD: Os objetos de Diretório Ativo Azure são introduzidos em CS AAD.

* Sincronização: Regras de **Sincronização de entrada** e regras de **sincronização** de saída são executados na ordem do número de precedência de baixo para superior. Para ver as Regras de Sincronização, pode ir ao Editor de Regras de **Sincronização** a partir das aplicações de desktop. As **Regras de Sincronização de Entrada** trazem dados de CS a MV. As Regras de **Sincronização de Saída** movem dados de MV para CS.

* Exportação para AD: Após a sincronização, os objetos são exportados de AD CS para **Ative Directory**.

* Exportação para AAD: Após a sincronização, os objetos são exportados de AAD CS para **o Azure Ative Directory**.

### <a name="step-by-step-investigation"></a>**Investigação passo a passo**

* Iniciaremos a nossa pesquisa a partir do **Metaverse** e olharemos para o mapeamento do atributo de origem para alvo.

* Lançar Gestor de Serviços de **Sincronização** a partir das aplicações de ambiente de trabalho, como mostrado abaixo:

  ![Gestor de Serviços de Sincronização de Lançamento](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* No Gestor de Serviço de **Sincronização,** selecione a **Pesquisa Metaverse,** selecione **Scope by Object Type,** selecione o objeto utilizando um atributo e clique no botão **'Procurar'.**

  ![Pesquisa metaversa](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Clique duas vezes no objeto encontrado na pesquisa **metaverso** para ver todos os seus atributos. Pode clicar no separador **Conectores** para ver o objeto correspondente em todos os espaços do **Conector**.

  ![Conectores de objetos metaversos](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Clique duas vezes no **Conector** de Diretório Ativo para visualizar os atributos do **Espaço do Conector.** Clique no botão **'Pré-visualização',** no seguinte dialogar no botão **'Visualização geração'.**

  ![Atributos espaciais do conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Agora clique no Fluxo de **Atributo sacar importação,** isto mostra fluxo de atributos do Espaço de **Conector de Diretório Ativo** para o **Metaverso**. **A** coluna Sync Rule mostra qual **a Regra de Sincronização** que contribuiu para esse atributo. **A** coluna Data Source mostra-lhe os atributos do **Espaço do Conector**. A coluna **Metaverse Attribute** mostra-lhe os atributos no **Metaverso**. Pode procurar o atributo não sincronizando aqui. Se não encontrar o atributo aqui, então este não está mapeado e tem que criar uma nova Regra de **Sincronização** personalizada para mapear o atributo.

  ![Atributos espaciais do conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Clique no Fluxo de **Atributo de Exportação** no painel esquerdo para ver o fluxo do atributo do **Metaverse** de volta ao Espaço de **Conector de Diretório Ativo** utilizando regras de **sincronização de saída**.

  ![Atributos espaciais do conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Da mesma forma, pode ver o objeto espaço do Connector de **Diretório Ativo Azure** e pode gerar a **Pré-Visualização** para visualizar o fluxo de atributos do **Metaverse** para o Espaço do **Conector** e vice-versa, desta forma pode investigar por que um atributo não está sincronizado.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Sincronização do Azure AD Connect: Conceitos Técnicos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Sincronização Azure AD Connect: Compreender a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Sincronização Azure AD Connect: Compreensão do Provisionamento Declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Sincronização Azure AD Connect: Compreender expressões de provisionamento declarativas](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Sincronização do Azure AD Connect: entender a configuração predefinida](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Sincronização azure AD Connect: Compreender utilizadores, grupos e contactos](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Sincronização Azure AD Connect: Atributos de sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Passos Seguintes

- [Sincronização Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md)
