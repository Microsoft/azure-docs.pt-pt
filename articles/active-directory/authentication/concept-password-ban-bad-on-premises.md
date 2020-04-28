---
title: Proteção de senhas Azure AD - Diretório Ativo Azure
description: Proibir senhas fracas no diretório ativo no local utilizando a proteção de senhas da AD Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74848651"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Enforce Azure AD password protection for Windows Server Active Directory (Aplicar a proteção de palavras-passe do Azure AD para o Windows Server Active Directory)

A proteção de senhas Azure AD é uma funcionalidade que melhora as políticas de senha seletiva seletiva numa organização. A implementação no local da proteção de passwords utiliza as listas globais e personalizadas de senha proibida que são armazenadas em Azure AD. Faz os mesmos controlos no local que o Azure AD faz para alterações baseadas na nuvem. Estas verificações são realizadas durante alterações de palavra-passe e cenários de redefinição de palavras-passe.

## <a name="design-principles"></a>Princípios de conceção

A proteção da palavra-passe Azure AD foi concebida com estes princípios em mente:

* Os controladores de domínio nunca têm de comunicar diretamente com a internet.
* Não são abertas novas portas de rede nos controladores de domínio.
* Não são necessárias alterações de esquemas de Diretório Ativo. O software utiliza o **recipiente** ative diretório existente e os objetos de esquema de **serviço ConnectionPoint.**
* Não é necessário um domínio de Diretório Ativo mínimo ou nível funcional florestal (DFL/FFL).
* O software não cria nem exige contas nos domínios do Diretório Ativo que protege.
* As palavras-passe de texto claro do utilizador nunca saem do controlador de domínio, quer durante as operações de validação de passwords, quer em qualquer outro momento.
* O software não depende de outras funcionalidades da Azure AD; por exemplo, o sincronismo de hash da palavra-passe Azure AD não está relacionado e não é necessário para que a proteção da palavra-passe Azure AD funcione.
* A implementação incremental é suportada, no entanto a política de palavra-passe só é aplicada quando o Agente controlador de domínio (Agente DC) está instalado. Consulte o próximo tópico para mais detalhes.

## <a name="incremental-deployment"></a>Implantação incremental

A proteção de palavras-passe Azure AD suporta a implantação incremental através de controladores de domínio num domínio de Diretório Ativo, mas é importante entender o que isso realmente significa e o que são as trocas.

O software de agente DC de proteção de senhas Azure AD só pode validar senhas quando é instalado num controlador de domínio, e apenas para alterações de palavra-passe enviadas para esse controlador de domínio. Não é possível controlar quais os controladores de domínio escolhidos pelas máquinas cliente do Windows para processar alterações de palavras-passe do utilizador. A fim de garantir um comportamento consistente e uma aplicação universal de segurança de proteção de senhas, o software do agente DC DEVE ser instalado em todos os controladores de domínio num domínio.

Muitas organizações vão querer fazer testes cuidadosos de proteção de senhas Azure AD num subconjunto dos seus controladores de domínio antes de fazer uma implementação completa. A proteção de palavras-passe Azure AD suporta a implementação parcial, ou seja, o software do agente DC num dado DC validará ativamente as palavras-passe mesmo quando outros DCs no domínio não tiverem o software do agente DC instalado. As implementações parciais deste tipo não são seguras e não são recomendadas para além dos ensaios.

## <a name="architectural-diagram"></a>Diagrama arquitetônico

É importante compreender os conceitos subjacentes ao design e função antes de implementar a proteção de senhas Azure AD num ambiente de Diretório Ativo no local. O diagrama que se segue mostra como os componentes da proteção das palavras-passe funcionam em conjunto:

![Como os componentes de proteção de senha seletiva Azure funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço de proxy de proteção de senhas Azure AD funciona em qualquer máquina unida ao domínio na atual floresta de Diretório Ativo. O seu principal objetivo é reencaminhar pedidos de descarregamento de políticas de password sabotados de controladores de domínio para a AD Azure. Em seguida, devolve as respostas da AD Azure ao controlador de domínio.
* O filtro de senha DLL do Agente DC recebe pedidos de validação de palavras-passe do utilizador do sistema operativo. Encaminha-os para o serviço de agente dc que está a funcionar localmente no controlador de domínio.
* O serviço de proteção de passwords do Agente DC recebe pedidos de validação de palavras-passe do filtro de senha DLL do Agente DC. Processa-os utilizando a atual política de passwords (disponível localmente) e devolve o resultado: *passar* ou *falhar*.

## <a name="how-password-protection-works"></a>Como funciona a proteção de passwords

Cada instância de serviço de proxy de proteção de senhas Azure AD anuncia-se aos controladores de domínio na floresta, criando um objeto **de serviçoConnectionPoint** no Diretório Ativo.

Cada serviço de agente DC para proteção de palavras-passe também cria um objeto **de serviçoConnectionPoint** no Diretório Ativo. Este objeto é utilizado principalmente para reportagens e diagnósticos.

O serviço dc agent é responsável por começar o download de uma nova política de senha saqueada pela Azure AD. O primeiro passo é localizar um serviço de proxy de proteção de senhas Azure AD consultando a floresta para os objetos **de proxy serviceConnectionPoint.** Quando um serviço de procuração disponível é encontrado, o Agente DC envia um pedido de descarregamento de política de senha para o serviço proxy. O serviço de procuração, por sua vez, envia o pedido para a Azure AD. O serviço de procuração devolve então a resposta ao serviço do Agente DC.

Depois de o serviço dc agent receber uma nova política de passwords da Azure AD, o serviço armazena a política numa pasta dedicada na raiz da sua quota de pasta *sysvol* de domínio. O serviço de Agente DC também monitoriza esta pasta no caso de políticas mais recentes se replicarem de outros serviços do Agente DC no domínio.

O serviço de Agente DC pede sempre uma nova política na startup de serviço. Após o início do serviço de Agente DC, verifica a idade da atual política disponível localmente de hora em hora. Se a apólice for superior a uma hora, o Agente DC solicita uma nova política da Azure AD através do serviço de procuração, como descrito anteriormente. Se a política atual não for mais de uma hora, o Agente dc continua a usar essa apólice.

Sempre que uma política de senha de proteção de senha seletiva azure é descarregada, essa política é específica para um inquilino. Por outras palavras, as políticas de palavra-passe são sempre uma combinação da lista global de senhas proibidas da Microsoft e da lista de senhas proibidas por inquilino.

O Agente DC comunica com o serviço de procuração via RPC através do TCP. O serviço proxy ouve estas chamadas numa porta RPC dinâmica ou estática, dependendo da configuração.

O Agente DC nunca ouve uma porta disponível em rede.

O serviço de procuração nunca liga para o serviço de agente dc.

O serviço de procuração é apátrida. Nunca caches políticas ou qualquer outro estado descarregado de Azure.

O serviço DC Agent usa sempre a mais recente política de senha disponível localmente para avaliar a palavra-passe de um utilizador. Se não houver uma política de senha disponível no DC local, a palavra-passe é automaticamente aceite. Quando isso acontece, uma mensagem de evento é registada para avisar o administrador.

A proteção da palavra-passe Azure AD não é um motor de aplicação de política em tempo real. Pode haver um atraso entre quando uma mudança de configuração da política de password é feita em Azure AD e quando essa mudança atinge e é aplicada em todos os controladores de domínio.

A proteção da palavra-passe Azure AD funciona como um suplemento às políticas de senha de diretório ativo existentes, e não como uma substituição. Isto inclui quaisquer outros dlls de filtro de senha de terceiros que possam ser instalados. O Diretório Ativo requer sempre que todos os componentes de validação de palavras-passe concordem antes de aceitar uma palavra-passe.

## <a name="foresttenant-binding-for-password-protection"></a>Ligação florestal/inquilino para proteção de senhas

A implantação da proteção de senhas Azure AD numa floresta de Diretório Ativo requer o registo dessa floresta com a AD Azure. Cada serviço de procuração que é implantado também deve ser registado na Azure AD. Estes registos florestais e proxy estão associados a um inquilino específico da AD Azure, que é identificado implicitamente pelas credenciais que são utilizadas durante o registo.

A floresta de Diretório Ativo e todos os serviços de procuração implantados dentro de uma floresta devem ser registados com o mesmo inquilino. Não é suportado ter uma floresta de Diretório Ativo ou quaisquer serviços de procuração nessa floresta sendo registados em diferentes inquilinos da AD Azure. Os sintomas de uma implementação tão mal configurada incluem a incapacidade de descarregar políticas de senha.

## <a name="download"></a>Transferência

Os dois instaladores de agentes necessários para a proteção de passwords Azure AD estão disponíveis no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passos seguintes
[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
