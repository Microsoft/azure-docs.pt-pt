---
title: 'Sincronização do Azure AD Connect: Conceitos técnicos | Documentos da Microsoft'
description: Explica os conceitos técnicos de sincronização do Azure AD Connect.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347591"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: Conceitos Técnicos
Este artigo é um resumo do tópico [arquitetura de compreensão](how-to-connect-sync-technical-concepts.md).

Sincronização do Azure AD Connect tem por base de uma plataforma de sincronização de metadiretório sólido.
As seções a seguir apresentam os conceitos para a sincronização de metadiretório.
Tendo como base o MIIS, ILM e FIM, o Azure Active Directory Sync Services fornece a plataforma seguinte para ligar a origens de dados, sincronização de dados entre origens de dados, bem como o aprovisionamento e desaprovisionamento de identidades.

![Conceitos Técnicos](./media/how-to-connect-sync-technical-concepts/scenario.png)

As secções seguintes fornecem mais detalhes sobre os seguintes aspectos do serviço de sincronização do FIM:

* Conector
* Fluxo de atributos
* Espaço conector
* Metaverse
* Aprovisionamento

## <a name="connector"></a>Conector
Os módulos de código que são utilizados para comunicar com um diretório conectado são chamados de conectores (anteriormente conhecidos como agentes de gestão (MAs)).

Estes são instalados no computador que executa a sincronização do Azure AD Connect. Os conectores oferecem a capacidade sem agente para conversar utilizando protocolos de sistema remoto em vez de contar com a implementação de agentes especializados. Isso significa que risco reduzido e tempos de implantação, especialmente ao lidar com sistemas e aplicativos críticos.

Na imagem acima, o conector é sinônimo de espaço conector, mas abrange todas as comunicações com o sistema externo.

O conector é responsável por todos os importar e exportar funcionalidade para o sistema e isenta os desenvolvedores a necessidade de saber como ligar para cada sistema de forma nativa ao utilizar o aprovisionamento declarativo para personalizar as transformações de dados.

Importações e exportações só ocorrerem quando agendada, permitindo insulation ainda mais contra alterações que ocorrem no sistema, uma vez que as alterações não serão automaticamente propagadas para a origem de dados ligada. Além disso, os desenvolvedores também podem criar seus próprios conectores para ligar a praticamente qualquer origem de dados.

## <a name="attribute-flow"></a>Fluxo de atributos
Metaverso é a vista consolidada de todas as identidades associado do vizinho espaços conectores. Na figura acima, o fluxo de atributos é descrito por linhas com arrowheads para o fluxo de entrada e saído. Fluxo de atributos é o processo de copiar ou transformar dados provenientes de um sistema para outro e todos os fluxos (entrados ou saídos) de atributo.

Fluxo de atributos ocorre entre o espaço conector e o metaverse bidirecionalmente quando operações de sincronização (completo ou delta) são agendadas para serem executadas.

Fluxo de atributos ocorre apenas quando estes sincronizações são executadas. Fluxos de atributos são definidos nas regras de sincronização. Estes podem ser (ISR na imagem acima) entrada ou saída (OSR na imagem acima).

## <a name="connected-system"></a>Sistema ligado
Sistema ligado (também conhecido como diretório ligado) é referir-se no sistema remoto do Azure AD Connect sync foi ligado ao e ler e escrever dados de identidade de e para.

## <a name="connector-space"></a>Espaço conector
Cada origem de dados ligada é representada como um subconjunto filtrado dos objetos e atributos no espaço conector.
Isso permite que o serviço de sincronização operar localmente, sem a necessidade de contactar o sistema remoto ao sincronizar os objetos e restringe a interação para importações e exporta apenas.

Quando a origem de dados e o conector tem a capacidade de fornecer uma lista de alterações (uma importação delta), em seguida, a eficiência operacional aumenta drasticamente como apenas as alterações, uma vez que o último ciclo de consulta são trocadas. O espaço conector separa a origem de dados ligada contra alterações propagar-se automaticamente ao exigir que o cronograma de conector importa e exporta. Dessa segurança adicionada concede-lhe tranquilidade durante os testes, a pré-visualizar ou confirmar a próxima atualização.

## <a name="metaverse"></a>Metaverse
Metaverso é a vista consolidada de todas as identidades associado do vizinho espaços conectores.

À medida que as identidades são vinculadas e autoridade é atribuída para vários atributos através de mapeamentos de fluxo de importação, o objeto de metaverso central é começa a agregam informações de vários sistemas. De que este fluxo de atributos de objeto, mapeamentos de transmitir informações para sistemas de saída.

Objetos são criados quando um sistema autoritativo projetos-los no metaverso. Assim que todas as ligações são removidas, o objeto de metaverso é eliminado.

Não não possível editar diretamente a objetos no metaverso. Todos os dados no objeto tem de ser fornecidos por meio do fluxo de atributos. Metaverso mantém conectores persistentes com cada espaço conector. Estes conectores não necessitar de reavaliação para cada execução de sincronização. Isso significa que o do Azure AD Connect não tem de localizar o objeto remoto correspondente cada vez. Isso evita a necessidade de agentes dispendiosas impedir alterações aos atributos que normalmente seria responsáveis para correlacionar os objetos.

Quando a detetar novas origens de dados que podem ter objetos preexistentes que têm de ser geridas, do Azure AD Connect utiliza um processo chamado de uma regra de associação para avaliar os possíveis candidatos com a qual estabelecer uma ligação.
Assim que a ligação é estabelecida, essa avaliação não ocorra novamente e fluxo de atributos normal pode ocorrer entre a origem de dados ligadas remoto e de metaverso.

## <a name="provisioning"></a>Aprovisionamento
Quando um projetos de código fonte autoritativa um novo objeto para o metaverso de um novo objeto de espaço conector pode ser criado no outro conector que representa uma origem de dados ligada downstream.

Isto inerentemente estabelece uma ligação e fluxo de atributos pode continuar bidirecionalmente.

Sempre que uma regra de determina que um novo objeto de espaço conector tem de ser criado, ele é chamado de aprovisionamento. No entanto, porque esta operação só ocorre dentro do espaço de conector, ele não passa para a origem de dados ligada até que uma exportação é executada.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
