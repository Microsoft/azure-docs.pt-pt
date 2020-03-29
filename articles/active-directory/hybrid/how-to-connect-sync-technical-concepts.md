---
title: 'Sincronização Azure AD Connect: Conceitos técnicos / Microsoft Docs'
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
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347591"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: Conceitos Técnicos
Este artigo é um resumo do tema [Understanding Architecture.](how-to-connect-sync-technical-concepts.md)

A sincronização Azure AD Connect baseia-se numa plataforma sólida de sincronização de metadiretórios.
As seguintes secções introduzem os conceitos de sincronização do metadireccional.
Com base no MIIS, ILM e FIM, o Azure Ative Directory Sync Services fornece a próxima plataforma de ligação a fontes de dados, sincronizando dados entre fontes de dados, bem como o fornecimento e desprovisionamento de identidades.

![Conceitos Técnicos](./media/how-to-connect-sync-technical-concepts/scenario.png)

As seguintes secções fornecem mais detalhes sobre os seguintes aspetos do Serviço de Sincronização FIM:

* Conector
* Fluxo de atributos
* Espaço conector
* Metaverso
* Aprovisionamento

## <a name="connector"></a>Conector
Os módulos de código que são utilizados para comunicar com um diretório conectado são chamados conectores (anteriormente conhecidos como agentes de gestão (MAs)).

Estes são instalados no computador que executa a sincronização Azure AD Connect. Os conectores proporcionam a capacidade de conversação sem agente utilizando protocolos de sistema remoto em vez de dependerem da implantação de agentes especializados. Isto significa diminuição dos tempos de risco e de implantação, especialmente quando se trata de aplicações e sistemas críticos.

Na imagem acima, o conector é sinónimo do espaço do conector, mas engloba toda a comunicação com o sistema externo.

O conector é responsável por toda a funcionalidade de importação e exportação para o sistema e liberta os desenvolvedores de precisarem de entender como se conectar a cada sistema de forma nativa quando utilizam o provisionamento declarativo para personalizar as transformações de dados.

As importações e exportações só ocorrem quando programadas, permitindo um maior isolamento das alterações ocorridas no sistema, uma vez que as alterações não se propagam automaticamente à fonte de dados ligada. Além disso, os desenvolvedores também podem criar os seus próprios conectores para se conectarem a praticamente qualquer fonte de dados.

## <a name="attribute-flow"></a>Fluxo de atributos
O metaverso é a visão consolidada de todas as identidades unidas dos espaços conector vizinhos. Na figura acima, o fluxo de atributos é representado por linhas com pontas de setas para o fluxo de entrada e saída. Fluxo de atributoé o processo de cópia ou transformação de dados de um sistema para outro e todos os fluxos de atributos (entrada ou saída).

O fluxo do atributo ocorre entre o espaço do conector e o metaverso bidirecional bidirecional quando as operações de sincronização (completa sincronia (completa ou delta) estão programadas para funcionar.

O fluxo do atributo só ocorre quando estas sincronizações são executadas. Os fluxos de atributos são definidos nas Regras de Sincronização. Estes podem ser de entrada (ISR na imagem acima) ou de saída (OSR na imagem acima).

## <a name="connected-system"></a>Sistema conectado
O sistema conectado (aka diretório conectado) está a referir-se ao sistema remoto Que o sistema operativo Azure AD Connect tem ligado e a ler e escrever dados de identidade de e para.

## <a name="connector-space"></a>Espaço conector
Cada fonte de dados ligada é representada como um subconjunto filtrado dos objetos e atributos no espaço do conector.
Isto permite que o serviço de sincronização funcione localmente sem a necessidade de contactar o sistema remoto ao sincronizar os objetos e restringe a interação apenas com importações e exportações.

Quando a fonte de dados e o conector têm a capacidade de fornecer uma lista de alterações (uma importação delta), então a eficiência operacional aumenta drasticamente à medida que apenas alterações desde que o último ciclo de votação é trocado. O espaço do conector isola a fonte de dados ligada das alterações que se propagam automaticamente, exigindo que o conector agende importações e exportações. Este seguro adicionado concede-lhe paz de espírito enquanto testa, pré-visualizaou ou confirma a próxima atualização.

## <a name="metaverse"></a>Metaverso
O metaverso é a visão consolidada de todas as identidades unidas dos espaços conector vizinhos.

Como as identidades estão ligadas entre si e a autoridade é atribuída para vários atributos através de mapeamento de fluxo de importação, o objeto metaverso central começa a agregar informações de vários sistemas. A partir deste fluxo de atributos do objeto, os mapeamentos transportam informações para sistemas de saída.

Os objetos são criados quando um sistema autoritário os projeta no metaverso. Assim que todas as ligações forem removidas, o objeto metaverso é eliminado.

Os objetos no metaverso não podem ser editados diretamente. Todos os dados do objeto devem ser contribuídos através do fluxo de atributos. O metaverso mantém conectores persistentes com cada espaço de conector. Estes conectores não requerem reavaliação para cada execução de sincronização. Isto significa que a sincronização Azure AD Connect não tem de localizar sempre o objeto remoto correspondente. Isto evita a necessidade de agentes dispendiosos para evitar alterações a atributos que normalmente seriam responsáveis pela correlação dos objetos.

Ao descobrir novas fontes de dados que possam ter objetos pré-existentes que precisam de ser geridos, o sincronizado Azure AD Connect utiliza um processo chamado regra de união para avaliar potenciais candidatos com os quais estabelecer uma ligação.
Uma vez estabelecida a ligação, esta avaliação não ocorre novamente e o fluxo normal de atributo pode ocorrer entre a fonte de dados ligada remotamente e o metaverso.

## <a name="provisioning"></a>Aprovisionamento
Quando uma fonte autoritária projeta um novo objeto no metaverso, um novo objeto espacial conector pode ser criado em outro Conector que representa uma fonte de dados conectada a jusante.

Isto inerentemente estabelece uma ligação, e o fluxo de atributopode prosseguir bidirecionalmente.

Sempre que uma regra determina que um novo objeto espacial conector precisa de ser criado, é chamado de provisionamento. No entanto, uma vez que esta operação só ocorre dentro do espaço do conector, não se transporta para a fonte de dados ligada até que seja efetuada uma exportação.

## <a name="additional-resources"></a>Recursos Adicionais
* [Azure AD Connect Sync: Opções de sincronização personalizadas](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
