---
title: Proteção de senha Azure AD - Azure Ative Directory
description: Proibir palavras-passe fracas no Ative Directy no local utilizando a proteção de senha AD do Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848651"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Enforce Azure AD password protection for Windows Server Active Directory (Aplicar a proteção de palavras-passe do Azure AD para o Windows Server Active Directory)

A proteção de senha AZure AD é uma funcionalidade que melhora as políticas de senha numa organização. No local, a implementação da proteção de palavras-passe utiliza as listas de senhas proibidas globais e personalizadas que são armazenadas em Azure AD. Faz os mesmos controlos no local que o Azure AD faz para alterações baseadas na nuvem. Estas verificações são realizadas durante as alterações de palavra-passe e cenários de reset da palavra-passe.

## <a name="design-principles"></a>Princípios de conceção

A proteção da palavra-passe AD AD é projetada com estes princípios em mente:

* Os controladores de domínio nunca têm de comunicar diretamente com a internet.
* Não são abertas novas portas de rede nos controladores de domínio.
* Não são necessárias alterações no esquema do Diretório Ativo. O software utiliza o recipiente de **ative** directy existente e os objetos de **sistema de serviçoConnectionPoint.**
* Não é necessário um domínio mínimo do Diretório Ativo ou um nível funcional florestal (DFL/FFL).
* O software não cria ou requer contas nos domínios do Ative Directory que protege.
* As palavras-passe de texto claro do utilizador nunca saem do controlador de domínio, quer durante as operações de validação de palavras-passe, quer em qualquer outro momento.
* O software não depende de outras funcionalidades AD Azure; por exemplo, a sincronização de hash de palavra-passe AD AZure não está relacionada e não é necessária para que a proteção da palavra-passe AD do Azure funcione.
* A implementação incremental é suportada, no entanto a política de palavra-passe só é aplicada quando o Agente controlador de domínio (Agente DC) está instalado. Consulte o próximo tópico para mais detalhes.

## <a name="incremental-deployment"></a>Implantação incremental

A proteção de passwords AD AZure suporta a implementação incremental através de controladores de domínio num domínio de Diretório Ativo, mas é importante entender o que isto realmente significa e o que são as compensações.

O software de proteção de passwords AD AD AD só pode validar palavras-passe quando esta é instalada num controlador de domínio, e apenas para alterações de palavra-passe que são enviadas para esse controlador de domínio. Não é possível controlar quais controladores de domínio são escolhidos pelas máquinas clientes do Windows para processar alterações na palavra-passe do utilizador. Para garantir um comportamento consistente e uma aplicação universal da proteção da palavra-passe, o software do agente DC DEVE ser instalado em todos os controladores de domínio num domínio.

Muitas organizações vão querer fazer testes cuidadosos da proteção de senha AZure AD num subconjunto dos seus controladores de domínio antes de fazerem uma implementação completa. A proteção de senha AD AD suporta a implementação parcial, ou seja, o software do agente DC num dado DC validará ativamente palavras-passe mesmo quando outros DCs no domínio não têm o software do agente DC instalado. As implementações parciais deste tipo NÃO são seguras e NÃO são recomendadas para além de testes.

## <a name="architectural-diagram"></a>Diagrama arquitetónico

É importante entender os conceitos de design e função subjacentes antes de implementar a proteção de senha AD AD em um ambiente de Ative Directory no local. O seguinte diagrama mostra como os componentes da proteção da palavra-passe funcionam em conjunto:

![Como os componentes de proteção de password AD AZure funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço Azure AD Password Protection Proxy funciona em qualquer máquina de domínio na atual floresta de Diretório Ativo. O seu principal objetivo é encaminhar pedidos de transferência de palavra-passe dos controladores de domínio para Azure AD. Em seguida, devolve as respostas do Azure AD ao controlador de domínio.
* O filtro de palavra-passe DLL do Agente DC recebe pedidos de validação de senha do utilizador do sistema operativo. Reencaminha-os para o serviço de agente de DC que está a funcionar localmente no controlador de domínio.
* O serviço dc agent de proteção de senhas recebe pedidos de validação de palavra-passe a partir do filtro de senha DLL do Agente DC. Processa-os utilizando a atual política de senha (disponível localmente) e devolve o resultado: *passe* ou *falhe*.

## <a name="how-password-protection-works"></a>Como funciona a proteção de palavras-passe

Cada instância de serviço de proteção de passwords Azure AD publica-se aos controladores de domínio na floresta, criando um objeto **de ServiceConnectionPoint** no Ative Directory.

Cada serviço de agente DC para proteção de palavras-passe também cria um objeto **de ServiçoConnectionPoint** no Ative Directory. Este objeto é usado principalmente para relatórios e diagnósticos.

O serviço dc agent é responsável por iniciar o download de uma nova política de senha a partir de Azure AD. O primeiro passo é localizar um serviço de proteção de senha azure AD, consultando a floresta para obter objetos **ProxyConnectionPoint.** Quando um serviço de procuração disponível é encontrado, o Agente DC envia um pedido de descarregamento da política de senha para o serviço de procuração. O serviço de procuração, por sua vez, envia o pedido à Azure AD. Em seguida, o serviço de procuração devolve a resposta ao serviço do Agente DC.

Depois de o serviço DC Agent receber uma nova política de senha da Azure AD, o serviço armazena a política numa pasta dedicada na raiz da sua partilha de pasta *sysvol de* domínio. O serviço dc agent também monitoriza esta pasta no caso de novas políticas se replicarem de outros serviços de Dc Agent no domínio.

O serviço dc agent sempre pede uma nova política no arranque de serviço. Após o início do serviço dc agent, verifica a idade da política disponível localmente por hora. Se a apólice for superior a uma hora, o Agente DC solicita uma nova política da Azure AD através do serviço de procuração, como descrito anteriormente. Se a apólice atual não for superior a uma hora, o agente de DC continua a usar essa apólice.

Sempre que uma política de senha de proteção de password AD Azure é descarregada, essa política é específica para um inquilino. Por outras palavras, as políticas de palavra-passe são sempre uma combinação da lista global de senhas proibidas da Microsoft e da lista de senhas proibidas por inquilino.

O Agente DC comunica com o serviço de procuração via RPC sobre TCP. O serviço proxy ouve estas chamadas numa porta RPC dinâmica ou estática, dependendo da configuração.

O Agente DC nunca ouve uma porta disponível em rede.

O serviço de procuração nunca liga para o serviço de agente de DC.

O serviço de procuração é apátrida. Nunca caches políticas ou qualquer outro estado descarregado de Azure.

O serviço DC Agent usa sempre a mais recente política de senha disponível localmente para avaliar a palavra-passe de um utilizador. Se não houver uma política de senha disponível no DC local, a palavra-passe é automaticamente aceite. Quando isso acontece, uma mensagem de evento é registada para avisar o administrador.

A proteção de passwords AD AZure não é um motor de aplicação de política em tempo real. Pode haver um atraso entre quando uma alteração da configuração da política de palavra-passe é feita em AD Azure e quando essa mudança atinge e é aplicada em todos os controladores de domínio.

A proteção da palavra-passe AD AD atua como um suplemento às políticas de senha do Ative Directory existentes, e não como uma substituição. Isto inclui quaisquer outros filtros de senha de 3ª parte que possam ser instalados. O Ative Directory exige sempre que todos os componentes de validação de passwords concordem antes de aceitar uma palavra-passe.

## <a name="foresttenant-binding-for-password-protection"></a>Ligação floresta/inquilino para proteção de senhas

A implantação da proteção de senha AZure AD numa floresta de Diretório Ativo requer o registo dessa floresta com Azure AD. Cada serviço de procuração que seja implantado também deve ser registado no Azure AD. Estes registos florestais e de procuração estão associados a um inquilino específico da AD Azure, que é identificado implicitamente pelas credenciais que são utilizadas durante o registo.

A floresta ative diretório e todos os serviços de procuração implantados dentro de uma floresta devem ser registados com o mesmo inquilino. Não é suportado para ter uma floresta de Diretório Ativo ou quaisquer serviços de procuração nessa floresta sendo registados para diferentes inquilinos da AD Azure. Os sintomas de tal implementação mal configurada incluem a incapacidade de descarregar políticas de senha.

## <a name="download"></a>Download

Os dois instaladores de agente necessários para a proteção de senha AD Azure estão disponíveis no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Próximos passos
[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
