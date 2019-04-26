---
title: Resolver problemas relacionados com um atributo não sincronizar no Azure AD Connect | Documentos da Microsoft
description: Este tópico fornece os passos sobre como resolver problemas com a sincronização de atributo utilizando a tarefa de resolução de problemas.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455963"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Resolver problemas relacionados com um atributo não sincronizar no Azure AD Connect

## <a name="recommended-steps"></a>**Passos Recomendados**

Antes de investigar problemas de sincronização de atributos, vamos compreender os **do Azure AD Connect** processo de sincronização:

  ![O processo de sincronização do Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço conector, uma tabela na base de dados.
* **MV:** Metaverso, uma tabela na base de dados.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Passos de sincronização**

* Importar do AD: Objetos do Active Directory são colocados em AD CS.

* Importar do AAD: Objetos do Active Directory do Azure são colocados em CS do AAD.

* Sincronização: **Regras de sincronização de entrada** e **regras de sincronização de saída** são executados na ordem de número de precedência de inferior para superior. Para ver as regras de sincronização, pode aceder à **Editor de regras de sincronização** das aplicações de ambiente de trabalho. O **regras de sincronização de entrada** traz dados a partir do CS para MV. O **regras de sincronização de saída** move dados de MV para CS.

* Exportar para o AD: Depois de executar a sincronização, objetos são exportados a partir do AD CS **do Active Directory**.

* Exportar para o AAD: Depois de executar a sincronização, objetos são exportados a partir do AAD CS **do Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Investigação de passo a passo**

* Começaremos nossa pesquisa do **Metaverso** e observe o mapeamento do atributo de origem ao destino.

* Inicie **Synchronization Service Manager** das aplicações de ambiente de trabalho, conforme apresentado abaixo:

  ![Inicie o Gestor do serviço de sincronização](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Sobre o **Synchronization Service Manager**, selecione a **pesquisa de Metaverso**, selecione **âmbito por tipo de objeto**, selecione o objeto usando um atributo e clique em **Pesquisa** botão.

  ![Pesquisa de Metaversos](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Faça duplo clique com o objeto encontrado no **Metaverso** pesquisa para ver todos os seus atributos. Pode clicar no **conectores** separador para examinar o objeto correspondente em todos os **espaços conectores do**.

  ![Conectores de objeto de Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Faça duplo clique no **conector do Active Directory** para ver a **espaço conector** atributos. Clique nas **pré-visualização** botão, o clique de caixa de diálogo seguinte na **gerar pré-visualização** botão.

  ![Atributos de espaço conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Agora, clique no **fluxo de atributos de importação**, isso mostra o fluxo de atributos da **espaço de conector do Active Directory** para o **Metaverso**. **Regra de sincronização** coluna mostra quais **regra de sincronização** contribuíram para esse atributo. **Origem de dados** coluna mostra os atributos a partir de **espaço conector**. **Atributo Metaverso** coluna mostra os atributos no **Metaverso**. Pode procurar o atributo não está a sincronizar aqui. Se não encontrar o atributo aqui, em seguida, isso não está mapeado e tem de criar personalizado novo **regra de sincronização** para mapear o atributo.

  ![Atributos de espaço conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Clique nas **exportar o fluxo de atributos** no painel da esquerda para ver o fluxo de atributos de **Metaverso** voltar ao **espaço de conector do Active Directory** usando  **Regras de sincronização de saída**.

  ![Atributos de espaço conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Da mesma forma, pode ver o **espaço de conector do Azure Active Directory** objeto e pode gerar o **pré-visualização** para ver o fluxo de atributos de **Metaverso** para o **Espaço conector** e vice-versa, desta forma pode investigar por que um atributo não estiver a sincronizar.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Sincronização do Azure AD Connect: Technical Concepts](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts) (Sincronização do Azure AD Connect: Conceitos Técnicos)
* [Sincronização do Azure AD Connect: Understanding the architecture](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture) (Sincronização do Azure AD Connect: Compreender a arquitetura)
* [Sincronização do Azure AD Connect: Understanding Declarative Provisioning](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning) (Sincronização do Azure AD Connect: Compreender o Aprovisionamento Declarativo)
* [Sincronização do Azure AD Connect: Understanding Declarative Provisioning Expressions](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions) (Sincronização do Azure AD Connect: Compreender as Expressões do Aprovisionamento Declarativo)
* [Sincronização do Azure AD Connect: Understanding the default configuration](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration) (Sincronização do Azure AD Connect: Compreender a configuração predefinida)
* [Sincronização do Azure AD Connect: Understanding Users, Groups, and Contacts](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts) (Sincronização do Azure AD Connect: Compreender Utilizadores e Contactos)
* [Sincronização do Azure AD Connect: Atributos sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Próximos Passos

- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida? ](whatis-hybrid-identity.md).
