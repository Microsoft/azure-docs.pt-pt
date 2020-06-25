---
title: Resolução de problemas um atributo que não está sincronizado no Azure AD Connect Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a sincronização de atributos usando a tarefa de resolução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a16e989a6da8daa4a290c7eaa4363eef09c9749
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85356343"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Resolução de problemas um atributo não sincronizado em Azure AD Connect

## <a name="recommended-steps"></a>**Passos Recomendados**

Antes de investigar os problemas de sincronização de atributos, vamos entender o processo de sincronização do **Azure AD Connect:**

  ![Processo de sincronização de ligação Azure AD](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço de conector, uma tabela na base de dados.
* **MV:** Metaverso, uma tabela na base de dados.
* **Anúncio:** Diretório Ativo
* **AAD:** Diretório Ativo Azure

### <a name="synchronization-steps"></a>**Etapas de sincronização**

* Importação a partir de AD: Os objetos de diretório ativo são trazidos para CS AD.

* Importação da AAD: Os objetos do Azure Ative Directory são trazidos para a AAD CS.

* Sincronização: **As Regras de Sincronização de Entrada** e As **Regras de Sincronização de Saída** são executadas na ordem do número de precedência de baixo para mais alto. Para ver as Regras de Sincronização, pode ir ao **Synchronization Rules Editor** a partir das aplicações para desktop. As **Regras de Sincronização de Entrada** trazem dados de CS para MV. As **Regras de Sincronização de Saída** transferem dados de MV para CS.

* Exportação para AD: Após a execução da sincronização, os objetos são exportados de CS AD para **Ative Directory**.

* Exportação para AAD: Após a sincronização, os objetos são exportados de AAD CS para **Azure Ative Directory**.

### <a name="step-by-step-investigation"></a>**Investigação passo a passo**

* Iniciaremos a nossa pesquisa a partir do **Metaverse** e veremos o mapeamento de atributos de origem para alvo.

* Launch **Synchronization Service Manager** a partir das aplicações para desktop, como mostrado abaixo:

  ![Gestor de Serviços de Sincronização de Lançamento](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* No Gestor de Serviço de **Sincronização**, selecione a **Pesquisa metaversa**, selecione **Scope by Object Type**, selecione o objeto usando um atributo e clique no botão **'Procurar'.**

  ![Pesquisa de Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Clique duas vezes no objeto encontrado na pesquisa **metaverse** para ver todos os seus atributos. Pode clicar no **separador Conectores** para ver o objeto correspondente em todos os **Espaços de Conector.**

  ![Conectores de objeto metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Clique duas vezes no **Conector ative Directory** para ver os atributos **do Espaço Do Conector.** Clique no botão **Pré-visualização,** no seguinte clique de diálogo no botão **'Gerar Pré-visualização'.**

  ![Atributos do espaço do conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Clique agora no **Fluxo de Atributos de Importação,** isto mostra o fluxo de atributos do **Ative Directory Connector Space** para o **Metaverse**. **A** coluna Sync Rule mostra qual **a Regra de Sincronização** que contribuiu para esse atributo. **A** coluna Data Source mostra-lhe os atributos do **Espaço Do Conector**. A coluna **Metaverse Attribute** mostra-lhe os atributos no **Metaverso**. Pode procurar o atributo não sincronizado aqui. Se não encontrar o atributo aqui, então este não está mapeado e tem de criar uma nova Regra de **Sincronização** personalizada para mapear o atributo.

  ![Atributos do espaço do conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Clique no **Fluxo de Atributos de Exportação** no painel esquerdo para ver o fluxo de atributos de **Metaverse** de volta para **o Espaço de Conector de Diretório Ativo** utilizando **regras de sincronização de saída**.

  ![Atributos do espaço do conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Da mesma forma, pode ver o objeto **Azure Ative Directory Connector Space** e pode gerar o **Preview** para visualizar o fluxo do atributo do **Metaverse** para o **Espaço do Conector** e vice-versa, desta forma pode investigar por que um atributo não está a sincronizar.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Azure AD Connect Sync: Conceitos Técnicos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect sync: Understanding the architecture](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect sync: Understanding Declarative Provisioning](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect sync: Understanding Declarative Provisioning Expressions](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Sincronização do Azure AD Connect: entender a configuração predefinida](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect sync: Understanding Users, Groups e Contacts](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect Sync: Shadow atributos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Passos Seguintes

- [Sincronização Azure Ad Connect](how-to-connect-sync-whatis.md).
- [O que é identidade híbrida?](whatis-hybrid-identity.md)
