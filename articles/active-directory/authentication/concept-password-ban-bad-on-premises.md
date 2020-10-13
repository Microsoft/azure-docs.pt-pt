---
title: Proteção de senha Azure AD - Diretório Ativo Azure
description: Proibir palavras-passe fracas em ambientes de serviços de domínio de diretório ativo no local utilizando a proteção de senha ad Ad Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4802c4faf245819f57f9885129fa876110407dd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965238"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Impor no local proteção de senha azure AD para serviços de domínio de diretório ativo

A Azure AD Password Protection deteta e bloqueia senhas fracas conhecidas e suas variantes, e também pode bloquear termos fracos adicionais que são específicos da sua organização. No local, a implementação da Azure AD Password Protection utiliza as mesmas listas de senhas banidas global e personalizadas que são armazenadas em Azure AD, e faz os mesmos controlos para alterações de senha no local, como o Azure AD faz para alterações baseadas na nuvem. Estas verificações são efetuadas durante alterações de palavras-passe e eventos de redefinição de palavras-passe contra controladores de domínio de Serviços de Domínio de Diretório Ativo (AD DS) no local.

## <a name="design-principles"></a>Princípios de conceção

A Azure AD Password Protection é projetado com os seguintes princípios em mente:

* Os controladores de domínio (DCs) nunca têm de comunicar diretamente com a internet.
* Não são abertas novas portas de rede em DCs.
* Não são necessárias alterações no esquema da AD DS. O software utiliza o *recipiente* DS existente e os objetos de *sistema de serviçoConnectionPoint.*
* Não é necessário um nível mínimo de DS AD ou de funcionamento florestal (DFL/FFL).
* O software não cria ou requer contas nos domínios DS AD que protege.
* As palavras-passe de texto claro do utilizador nunca saem do controlador de domínio, quer durante as operações de validação de palavras-passe, quer em qualquer outro momento.
* O software não depende de outras funcionalidades AZure AD. Por exemplo, a sincronização de hash de palavra-passe AD (PHS) não está relacionada ou necessária para a Proteção de PasswordS AD Azure.
* A implementação incremental é suportada, no entanto a política de palavra-passe só é aplicada quando o Agente controlador de domínio (Agente DC) está instalado.

## <a name="incremental-deployment"></a>Implantação incremental

A Azure AD Password Protection suporta a implementação incremental em DCs num domínio DS AD. É importante entender o que isto realmente significa e o que são as trocas.

O software do agente Azure AD Password Protection DC só pode validar palavras-passe quando está instalado num DC, e apenas para alterações de palavra-passe que são enviadas para esse DC. Não é possível controlar quais DCs são escolhidos por máquinas clientes Do Windows para processar alterações de palavras-passe do utilizador. Para garantir um comportamento consistente e uma aplicação universal da proteção contra a palavra-passe Azure AD, o software do agente DC deve ser instalado em todos os DCs num domínio.

Muitas organizações querem testar cuidadosamente a Proteção de Passwords Azure AD num subconjunto dos seus DCs antes de uma implementação completa. Para suportar este cenário, a Azure AD Password Protection suporta a implementação parcial. O software do agente DC num dado DC valida ativamente as palavras-passe, mesmo quando outros DCs no domínio não têm o software do agente DC instalado. As implementações parciais deste tipo não são seguras e não são recomendadas para além de testes.

## <a name="architectural-diagram"></a>Diagrama arquitetónico

É importante entender os conceitos de design e função subjacentes antes de implementar a Proteção de Password AD AZure num ambiente AD DS no local. O seguinte diagrama mostra como os componentes da Proteção de Senha AD AZure funcionam em conjunto:

![Como os componentes de proteção de senha AD Azure funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço Azure AD Password Protection Proxy funciona em qualquer máquina unida a domínio na atual floresta AD DS. O principal objetivo do serviço é encaminhar pedidos de transferência de palavra-passe de DCs para Azure AD e, em seguida, devolver as respostas do AZure AD para o DC.
* O filtro de palavra-passe DLL do Agente DC recebe pedidos de validação de senha do utilizador do sistema operativo. O filtro encaminha-os para o serviço de agente dc que está a funcionar localmente na DC.
* O serviço dc agent da Azure AD Password Protection recebe pedidos de validação de palavra-passe a partir do filtro de senha DLL do Agente DC. O serviço dc agent processa-os utilizando a atual política de senha (disponível localmente) e devolve o resultado do *passe* ou *falha*.

## <a name="how-azure-ad-password-protection-works"></a>Como funciona a Proteção de Passwords AD AZure

Os componentes de proteção de senha azure AD funcionam da seguinte forma:

1. Cada instância de serviço de proteção de senha Azure AD, anuncia-se aos DCs na floresta, criando um objeto *de ServiceConnectionPoint* no Ative Directory.

    Cada serviço de agente DC para a proteção de senhas Azure AD também cria um objeto *de serviçoConnectionPoint* no Ative Directory. Este objeto é usado principalmente para relatórios e diagnósticos.

1. O serviço dc agent é responsável por iniciar o download de uma nova política de senha a partir de Azure AD. O primeiro passo é localizar um serviço de proteção de senha azure AD, consultando a floresta para obter objetos *ProxyConnectionPoint.*

1. Quando um serviço de procuração disponível é encontrado, o Agente DC envia um pedido de descarregamento da política de senha para o serviço de procuração. O serviço de procuração, por sua vez, envia o pedido para a Azure AD, em seguida, devolve a resposta ao serviço de Agente DC.

1. Depois de o serviço DC Agent receber uma nova política de senha da Azure AD, o serviço armazena a política numa pasta dedicada na raiz da sua partilha de pasta *sysvol de* domínio. O serviço dc agent também monitoriza esta pasta no caso de novas políticas se replicarem de outros serviços de Dc Agent no domínio.

1. O serviço dc agent sempre pede uma nova política no arranque de serviço. Após o início do serviço dc agent, verifica a idade da política disponível localmente por hora. Se a apólice for superior a uma hora, o Agente DC solicita uma nova política da Azure AD através do serviço de procuração, como descrito anteriormente. Se a apólice atual não for superior a uma hora, o agente de DC continua a usar essa apólice.

1. Quando os eventos de alteração de palavra-passe são recebidos por um DC, a política em cache é usada para determinar se a nova palavra-passe é aceite ou rejeitada.

### <a name="key-considerations-and-features"></a>Principais considerações e características

* Sempre que uma política de senha de proteção de senha AZure AD é descarregada, essa política é específica para um inquilino. Por outras palavras, as políticas de palavra-passe são sempre uma combinação da lista global de senhas proibidas da Microsoft e da lista de senhas proibidas por inquilino.
* O Agente DC comunica com o serviço de procuração via RPC sobre TCP. O serviço proxy ouve estas chamadas numa porta RPC dinâmica ou estática, dependendo da configuração.
* O Agente DC nunca ouve uma porta disponível em rede.
* O serviço de procuração nunca liga para o serviço de agente de DC.
* O serviço de procuração é apátrida. Nunca caches políticas ou qualquer outro estado descarregado de Azure.
* O serviço DC Agent usa sempre a mais recente política de senha disponível localmente para avaliar a palavra-passe de um utilizador. Se não houver uma política de senha disponível no DC local, a palavra-passe é automaticamente aceite. Quando isso acontece, uma mensagem de evento é registada para avisar o administrador.
* A Azure AD Password Protection não é um motor de aplicação de políticas em tempo real. Pode haver um atraso entre quando uma alteração da configuração da política de palavra-passe é feita em AZure AD e quando essa mudança atinge e é aplicada em todos os DCs.
* A Azure AD Password Protection funciona como um suplemento às políticas de senhas de DS AD existentes, e não como uma substituição. Isto inclui quaisquer outros filtros de senha de 3ª parte que possam ser instalados. O DS AD exige sempre que todos os componentes de validação de palavras-passe concordem antes de aceitar uma palavra-passe.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Ligação florestal/inquilino para proteção de senha AD Azure

A implantação da Azure AD Password Protection numa floresta AD DS requer o registo dessa floresta com Azure AD. Cada serviço de procuração que seja implantado também deve ser registado no Azure AD. Estes registos florestais e de procuração estão associados a um inquilino específico da AD Azure, que é identificado implicitamente pelas credenciais que são utilizadas durante o registo.

A floresta AD DS e todos os serviços de procuração implantados dentro de uma floresta devem ser registados com o mesmo inquilino. Não é suportado para ter uma floresta AD DS ou qualquer serviço de procuração naquela floresta sendo registrado para diferentes inquilinos AD AZure. Os sintomas de tal implementação mal configurada incluem a incapacidade de descarregar políticas de senha.

> [!NOTE]
> Os clientes que têm vários inquilinos AD Azure devem, portanto, escolher um inquilino distinto para registar cada floresta para efeitos de Proteção de Senha Azure AD.

## <a name="download"></a>Download

Os dois instaladores de agente necessários para a Proteção de Passwords AD AZure estão disponíveis no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar no local Azure AD Password Protection, complete o seguinte como:

> [!div class="nextstepaction"]
> [Implementar no local Azure AD Password Protection](howto-password-ban-bad-on-premises-deploy.md)
