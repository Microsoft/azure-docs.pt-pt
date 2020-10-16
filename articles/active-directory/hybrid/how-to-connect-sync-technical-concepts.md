---
title: 'Azure AD Connect sincronização: Conceitos técnicos / Microsoft Docs'
description: Explica os conceitos técnicos da sincronização Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01e53b30a4c27296e30e031ffb771697afa8e1e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87019680"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: Conceitos Técnicos
Este artigo é um resumo do tema [Compreender a arquitetura.](how-to-connect-sync-technical-concepts.md)

A azure AD Connect sync baseia-se numa plataforma sólida de sincronização metadoriory.
As seguintes secções introduzem os conceitos de sincronização metadoritro.
Com base no MIIS (Microsoft Identity Integration Server), ILM (Identity Lifecycle Manager) e FIM (Gestor de Identidade de Vanguarda), o Azure Ative Directory Sync Services fornece a próxima plataforma para ligação a fontes de dados, sincronização de dados entre fontes de dados, bem como o fornecimento e desprovisionamento de identidades.

![Conceitos Técnicos](./media/how-to-connect-sync-technical-concepts/scenario.png)

As seguintes secções fornecem mais detalhes sobre os seguintes aspetos do Serviço de Sincronização FIM:

* Conector
* Fluxo de atributos
* Espaço de conector
* Metaverso
* Aprovisionamento

## <a name="connector"></a>Conector
Os módulos de código que são utilizados para comunicar com um diretório conectado são chamados conectores (anteriormente conhecidos como agentes de gestão (MAs)).

Estes estão instalados no computador que executa a sincronização Azure AD Connect. Os conectores fornecem a capacidade de conversação sem agente utilizando protocolos de sistema remoto em vez de depender da implantação de agentes especializados. Isto significa a diminuição dos tempos de risco e de implantação, especialmente quando se trata de aplicações e sistemas críticos.

Na imagem acima, o conector é sinónimo do espaço do conector, mas engloba toda a comunicação com o sistema externo.

O conector é responsável por toda a funcionalidade de importação e exportação para o sistema e liberta os desenvolvedores de precisarem de entender como se conectarem a cada sistema de forma nativa ao utilizarem o fornecimento declarativo para personalizar as transformações de dados.

As importações e exportações só ocorrem quando programados, permitindo um maior isolamento das alterações que ocorrem no sistema, uma vez que as alterações não se propagam automaticamente à fonte de dados ligada. Além disso, os desenvolvedores também podem criar os seus próprios conectores para se conectarem a praticamente qualquer fonte de dados.

## <a name="attribute-flow"></a>Fluxo de atributos
O metaverso é a visão consolidada de todas as identidades unidas dos espaços de conector vizinhos. Na figura acima, o fluxo de atributos é representado por linhas com pontas de setas para o fluxo de entrada e saída. O fluxo de atributos é o processo de copiar ou transformar dados de um sistema para outro e todos os fluxos de atributos (entrada ou saída).

O fluxo de atributos ocorre entre o espaço do conector e o metaverso bi-direccional quando estão programadas operações de sincronização (completas ou delta).

O fluxo de atributos só ocorre quando estas sincronizações são executadas. Os fluxos de atributos são definidos nas Regras de Sincronização. Estes podem ser de entrada (ISR na imagem acima) ou de saída (OSR na imagem acima).

## <a name="connected-system"></a>Sistema conectado
O sistema conectado (também conhecido como diretório ligado) refere-se ao sistema remoto Azure AD Connect sync que ligou e lê e escreve dados de identidade de e para.

## <a name="connector-space"></a>Espaço de conector
Cada fonte de dados ligada é representada como um subconjunto filtrado dos objetos e atributos no espaço do conector.
Isto permite que o serviço de sincronização funcione localmente sem a necessidade de contactar o sistema remoto ao sincronizar os objetos e restringir a interação apenas às importações e exportações.

Quando a fonte de dados e o conector têm a capacidade de fornecer uma lista de alterações (uma importação delta), então a eficiência operacional aumenta drasticamente à medida que apenas muda desde que o último ciclo de votação é trocado. O espaço do conector isola a fonte de dados ligada das alterações que se propagam automaticamente, exigindo que o conector marque importações e exportações. Este seguro adicional concede-lhe paz de espírito durante os testes, pré-visualização ou confirmação da próxima atualização.

## <a name="metaverse"></a>Metaverso
O metaverso é a visão consolidada de todas as identidades unidas dos espaços de conector vizinhos.

Como as identidades estão ligadas entre si e a autoridade é atribuída para vários atributos através de mapeamentos de fluxo de importação, o objeto metaverso central começa a agregar informação de vários sistemas. A partir deste objeto atributo fluxo, os mapeamentos transportam informações para sistemas de saída.

Os objetos são criados quando um sistema autoritário os projeta para o metaverso. Assim que todas as ligações forem removidas, o objeto metaverso é eliminado.

Os objetos no metaverso não podem ser editados diretamente. Todos os dados do objeto devem ser contribuídos através do fluxo de atributos. O metaverso mantém conectores persistentes com cada espaço do conector. Estes conectores não necessitam de reavaliação para cada execução de sincronização. Isto significa que a sincronização Azure AD Connect não tem de localizar o objeto remoto correspondente de cada vez. Isto evita a necessidade de agentes dispendiosos para evitar alterações a atributos que normalmente seriam responsáveis por correlacionar os objetos.

Ao descobrir novas fontes de dados que possam ter objetos pré-existentes que precisam de ser geridos, o Azure AD Connect sincronizado utiliza um processo chamado regra de união para avaliar potenciais candidatos com os quais estabelecer uma ligação.
Uma vez estabelecida a ligação, esta avaliação não ocorre novamente e o fluxo de atributos normal pode ocorrer entre a fonte de dados conectada remota e o metaverso.

## <a name="provisioning"></a>Aprovisionamento
Quando uma fonte autoritária projeta um novo objeto no metaverso, um novo objeto espacial do conector pode ser criado em outro Conector que representa uma fonte de dados ligada a jusante.

Isto estabelece inerentemente uma ligação, e o fluxo de atributos pode proceder bi-direcionalmente.

Sempre que uma regra determina que um novo objeto de espaço do conector precisa de ser criado, é chamado de provisionamento. No entanto, uma vez que esta operação ocorre apenas dentro do espaço do conector, não se transporta para a fonte de dados ligada até que seja realizada uma exportação.

## <a name="additional-resources"></a>Recursos Adicionais
* [Azure AD Connect Sync: Personalizar opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
